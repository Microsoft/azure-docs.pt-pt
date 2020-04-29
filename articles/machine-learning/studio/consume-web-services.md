---
title: Consumir serviço web
titleSuffix: ML Studio (classic) - Azure
description: Uma vez que um serviço de machine learning é implantado a partir do Azure Machine Learning Studio (clássico), o serviço RESTFul Web pode ser consumido quer como serviço de resposta a pedidos em tempo real ou como um serviço de execução de lotes.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 06/02/2017
ms.openlocfilehash: b97fe6e55e2c36b6f101071e702952f529146281
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80631663"
---
# <a name="how-to-consume-an-azure-machine-learning-studio-classic-web-service"></a>Como consumir um serviço web Azure Machine Learning Studio (clássico)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Assim que implementar um modelo de aprendizagem automática Azure (clássico) como um serviço Web, pode utilizar uma API REST para enviar dados e obter previsões. Pode enviar os dados em tempo real ou em modo de lote.

Pode encontrar mais informações sobre como criar e implementar um serviço Web de Aprendizagem Automática utilizando o Machine Learning Studio (clássico) aqui:

* Para um tutorial sobre como criar uma experiência no Machine Learning Studio (clássico), consulte [Create your first experiment](create-experiment.md).
* Para mais detalhes sobre como implementar um serviço Web, consulte Implementar um serviço Web de [aprendizagem automática](deploy-a-machine-learning-web-service.md).
* Para mais informações sobre machine learning em geral, visite o Centro de Documentação de [Aprendizagem Automática.](https://azure.microsoft.com/documentation/services/machine-learning/)



## <a name="overview"></a>Descrição geral
Com o serviço Web azure machine learning, uma aplicação externa comunica com um modelo de pontuação de fluxo de trabalho de Machine Learning em tempo real. Uma chamada de serviço Web de aprendizagem automática devolve os resultados da previsão a uma aplicação externa. Para fazer uma chamada de serviço Web de aprendizagem automática, passa uma chave API que é criada quando implementa uma previsão. O serviço Machine Learning Web baseia-se no REST, uma escolha popular de arquitetura para projetos de programação web.

O Azure Machine Learning Studio (clássico) tem dois tipos de serviços:

* Serviço de Resposta a Pedidos (RRS) – Um serviço de baixa latência e altamente escalável que fornece uma interface para os modelos apátridas criados e implantados a partir do Machine Learning Studio (clássico).
* Serviço de Execução de Lote (BES) – Um serviço assíncrono que marca um lote para registos de dados.

Para obter mais informações sobre os serviços Web de Aprendizagem automática, consulte Implementar um serviço Web de [aprendizagem automática](deploy-a-machine-learning-web-service.md).

## <a name="get-an-authorization-key"></a>Obtenha uma chave de autorização
Quando implementa a sua experiência, as teclas API são geradas para o serviço Web. Pode recuperar as chaves de vários locais.

### <a name="from-the-microsoft-azure-machine-learning-web-services-portal"></a>Do portal microsoft Azure Machine Learning Web Services
Inscreva-se no portal [microsoft Azure Machine Learning Web Services.](https://services.azureml.net)

Para recuperar a chave API para um novo serviço Web de aprendizagem automática:

1. No portal Azure Machine Learning Web Services, clique em **Web Services** no menu superior.
2. Clique no serviço Web para o qual pretende recuperar a chave.
3. No menu superior, clique **em Consumir**.
4. Copiar e guardar a **Chave Primária.**

Para recuperar a chave API para um serviço Web clássico de aprendizagem automática:

1. No portal Azure Machine Learning Web Services, clique em **Serviços Web Clássicos** no menu superior.
2. Clique no serviço Web com o qual está a trabalhar.
3. Clique no ponto final para o qual pretende recuperar a chave.
4. No menu superior, clique **em Consumir**.
5. Copiar e guardar a **Chave Primária.**

### <a name="classic-web-service"></a>Serviço Web clássico
 Também pode recuperar uma chave para um serviço Web Clássico do Machine Learning Studio (clássico).

#### <a name="machine-learning-studio-classic"></a>Machine Learning Studio (clássico)
1. No Machine Learning Studio (clássico), clique em **WEB SERVICES** à esquerda.
2. Clique num serviço Web. A **tecla API** está no **separador DASHBOARD.**

## <a name="connect-to-a-machine-learning-web-service"></a><a id="connect"></a>Ligue-se a um serviço Web de aprendizagem automática
Pode ligar-se a um serviço Web de Aprendizagem Automática utilizando qualquer linguagem de programação que suporte o pedido e resposta http. Pode ver exemplos em C#, Python e R a partir de uma página de ajuda do serviço Web machine learning.

**Ajuda da API de Aprendizagem automática** A ajuda da Machine Learning API é criada quando implementa um serviço Web. Ver [Tutorial 3: Implementar modelo de risco de crédito](tutorial-part3-credit-risk-deploy.md).
A ajuda da Machine Learning API contém detalhes sobre um serviço Web de previsão.

1. Clique no serviço Web com o qual está a trabalhar.
2. Clique no ponto final para o qual pretende ver a Página de Ajuda DaAPi.
3. No menu superior, clique **em Consumir**.
4. Clique na página de **ajuda da API** nos pontos finais de Pedido-Resposta ou execução de lote.

**Para ver a ajuda da Machine Learning API para um novo serviço Web**

No Portal de Serviços Web de [Aprendizagem automática Azure:](https://services.azureml.net/)

1. Clique em **WEB SERVICES** no menu superior.
2. Clique no serviço Web para o qual pretende recuperar a chave.

Clique em **Utilizar o Serviço Web** para obter os URIs para os Serviços de Resposta a Pedido e Execução de Lotes e código de amostra em C#, R e Python.

Clique na **API swagger** para obter documentação baseada em Swagger para as APIs chamadas a partir dos URIs fornecidos.

### <a name="c-sample"></a>C# Amostra
Para se ligar a um serviço Web de Aprendizagem Automática, utilize um **HttpClient** passando o ScoreData. O ScoreData contém um Vetor de Recurso, um vetor n-dimensional de características numéricas que representa o ScoreData. Autentica-se no serviço de Machine Learning com uma chave API.

Para se ligar a um serviço Web de aprendizagem automática, o pacote **NuGet Microsoft.AspNet.WebApi.Client** NuGet deve ser instalado.

**Instale Microsoft.AspNet.webApi.Client NuGet no Estúdio Visual**

1. Publique o conjunto de dados de Descarregamento da UCI: Adult 2 class dataset Web Service.
2. Clique em **ferramentas** > **nuget pacote manager** > **consola**.
3. Escolha **instalar-pacote Microsoft.AspNet.webApi.Client**.

**Para executar a amostra de código**

1. Publique a experiência "Sample 1: Download dataset from UCI: Adult 2 class dataset", parte da recolha de amostras de Machine Learning.
2. Atribuir apiKey com a chave de um serviço Web. Consulte **a chave de autorização** acima.
3. Atribuir serviçoUri com o Pedido URI.

**Aqui está o que um pedido completo será.**
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

### <a name="python-sample"></a>Amostra de Python
Para se ligar a um serviço Web de Aprendizagem Automática, utilize a biblioteca **urllib2** para Python 2.X e **urllib.request** library for Python 3.X. Passará o ScoreData, que contém um Vetor de Recurso, um vetor n-dimensional de características numéricas que representa o ScoreData. Autentica-se no serviço de Machine Learning com uma chave API.

**Para executar a amostra de código**

1. Implemente a experiência "Sample 1: Download dataset from UCI: Adult 2 class dataset", parte da recolha de amostras de Machine Learning.
2. Atribuir apiKey com a chave de um serviço Web. Consulte a secção **chave de autorização Perto** do início deste artigo.
3. Atribuir serviçoUri com o Pedido URI.

**Aqui está o que um pedido completo será.**
```python
import urllib2 # urllib.request and urllib.error for Python 3.X
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

# "urllib.request.Request(url, body, headers)" for Python 3.X
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

### <a name="r-sample"></a>Amostra R

Para ligar a um Serviço Web de Aprendizagem Automática, utilize as bibliotecas **RCurl** e **Rjson** para fazer o pedido e processar a resposta jSON devolvida. Passará o ScoreData, que contém um Vetor de Recurso, um vetor n-dimensional de características numéricas que representa o ScoreData. Autentica-se no serviço de Machine Learning com uma chave API.

**Aqui está o que um pedido completo será.**
```r
library("RCurl")
library("rjson")

# Accept TLS/SSL certificates issued by public Certificate Authorities
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

### <a name="javascript-sample"></a>Amostra javascript

Para se ligar a um Serviço Web de Aprendizagem Automática, utilize o pacote **npm solicitado** no seu projeto. Também utilizará `JSON` o objeto para formatar a sua entrada e analisar o resultado. Instale `npm install request --save`usando, `"request": "*"` ou adicione ao seu `dependencies` pacote.json por baixo e executado `npm install`.

**Aqui está o que um pedido completo será.**
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
