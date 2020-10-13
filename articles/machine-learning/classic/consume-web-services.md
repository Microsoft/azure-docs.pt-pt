---
title: 'Estúdio ML (clássico): Consumir serviços web - Azure'
description: Uma vez que um serviço de machine learning é implantado a partir do Azure Machine Learning Studio (clássico), o serviço WEB RESTFul pode ser consumido quer como serviço de resposta a pedidos em tempo real, quer como um serviço de execução de lote.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: seodec18, devx-track-python, devx-track-js, devx-track-csharp
ms.date: 05/29/2020
ms.openlocfilehash: 66c9368d06960790c6dc960d2d95bd82be1a6e39
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91345964"
---
# <a name="how-to-consume-a-machine-learning-studio-classic-web-service"></a>Como consumir um serviço web Machine Learning Studio (clássico)

**APLICA-SE A:** ![ sim ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (clássico) no ![ ](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../compare-azure-ml-to-studio-classic.md)  


Uma vez que você implemente um modelo preditivo Azure Machine Learning Studio (clássico) como um serviço Web, você pode usar uma API REST para enviar-lhe dados e obter previsões. Pode enviar os dados em tempo real ou em modo de lote.

Pode encontrar mais informações sobre como criar e implementar um serviço Web machine learning utilizando o Machine Learning Studio (clássico) aqui:

* Para um tutorial sobre como criar uma experiência no Machine Learning Studio (clássico), consulte [Criar a sua primeira experiência.](create-experiment.md)
* Para obter detalhes sobre como implementar um serviço Web, consulte [implementar um serviço Web machine learning](deploy-a-machine-learning-web-service.md).
* Para mais informações sobre machine learning em geral, visite o [Centro de Documentação de Aprendizagem automática.](https://azure.microsoft.com/documentation/services/machine-learning/)



## <a name="overview"></a>Descrição geral
Com o serviço Web Azure Machine Learning, uma aplicação externa comunica com um modelo de pontuação de fluxo de trabalho machine learning em tempo real. Uma chamada de serviço Web machine learning retorna os resultados da previsão a uma aplicação externa. Para fazer uma chamada de serviço Web machine learning, você passa uma chave API que é criada quando implementa uma previsão. O serviço Web Machine Learning é baseado no REST, uma escolha popular de arquitetura para projetos de programação web.

O Azure Machine Learning Studio (clássico) tem dois tipos de serviços:

* Request-Response Service (RRS) – Um serviço de baixa latência, altamente escalável que fornece uma interface aos modelos apátridas criados e implantados a partir do Machine Learning Studio (clássico).
* Serviço de Execução de Lotes (BES) – Um serviço assíncronos que marca um lote para registos de dados.

Para obter mais informações sobre serviços Web machine learning, consulte [implementar um serviço Web de machine learning](deploy-a-machine-learning-web-service.md).

## <a name="get-an-authorization-key"></a>Obtenha uma chave de autorização
Quando implementa a sua experiência, as teclas API são geradas para o serviço Web. Pode recuperar as chaves de vários locais.

### <a name="from-the-microsoft-azure-machine-learning-web-services-portal"></a>Do portal Microsoft Azure Machine Learning Web Services
Inscreva-se no portal [Microsoft Azure Machine Learning Web Services.](https://services.azureml.net)

Para recuperar a chave API para um serviço Web new machine learning:

1. No portal Azure Machine Learning Web Services, clique nos **Serviços Web** no menu superior.
2. Clique no serviço Web para o qual deseja recuperar a chave.
3. No menu superior, clique em **Consumir.**
4. Copie e guarde a **chave primária.**

Para recuperar a chave API para um serviço Web de Machine Learning Classic:

1. No portal Azure Machine Learning Web Services, clique em **Classic Web Services** no menu de topo.
2. Clique no serviço Web com o qual está a trabalhar.
3. Clique no ponto final para o qual pretende recuperar a chave.
4. No menu superior, clique em **Consumir.**
5. Copie e guarde a **chave primária.**

### <a name="classic-web-service"></a>Serviço Web clássico
 Também pode obter uma chave para um serviço Web Clássico do Machine Learning Studio (clássico).

#### <a name="machine-learning-studio-classic"></a>Machine Learning Studio (clássico)
1. No Machine Learning Studio (clássico), clique nos **SERVIÇOS WEB** à esquerda.
2. Clique num serviço Web. A **tecla API** está no **separador DASHBOARD.**

## <a name="connect-to-a-machine-learning-web-service"></a><a id="connect"></a>Ligue-se a um serviço Web de Machine Learning
Pode ligar-se a um serviço Web machine learning utilizando qualquer linguagem de programação que suporte pedido e resposta HTTP. Você pode ver exemplos em C#, Python e R a partir de uma página de ajuda de serviço web machine learning.

**Ajuda da API de Machine Learning** A ajuda da API machine learning é criada quando implementa um serviço Web. Ver [Tutorial 3: Implementar modelo de risco de crédito](tutorial-part3-credit-risk-deploy.md).
A ajuda da API machine learning contém detalhes sobre um serviço Web de previsão.

1. Clique no serviço Web com o qual está a trabalhar.
2. Clique no ponto final para o qual deseja ver a Página de Ajuda da API.
3. No menu superior, clique em **Consumir.**
4. Clique na **página de ajuda da API** nos pontos finais de Request-Response ou execução de lote.

**Para ver a ajuda da API machine learning para um novo serviço Web**

No [Portal Azure Machine Learning Web Services](https://services.azureml.net/):

1. Clique nos **SERVIÇOS WEB** no menu superior.
2. Clique no serviço Web para o qual deseja recuperar a chave.

Clique em Utilizar o **Serviço Web** para obter os URIs para os serviços de execução de Request-Response e lote e código de amostra em C#, R e Python.

Clique **em Swagger API** para obter documentação baseada em Swagger para as APIs chamadas a partir dos URIs fornecidos.

### <a name="c-sample"></a>Amostra C#
Para ligar a um serviço Web machine learning, utilize um ScoreData de passagem **httpClient.** O ScoreData contém um FeatureVector, um vetor n-dimensional de características numéricas que representa o ScoreData. Autentica-se o serviço de Machine Learning com uma chave API.

Para se ligar a um serviço Web machine learning, o pacote **Microsoft.AspNet.WebApi.Client** NuGet deve ser instalado.

**Instale Microsoft.AspNet.WebApi.Client NuGet em Estúdio Visual**

1. Publique o conjunto de dados de descarregamento da UCI: Adult 2 class dataset Web Service.
2. Clique em **Ferramentas**  >  **NuGet Package Manager**Package Manager  >  **Consola**.
3. Escolha **instalar pacote Microsoft.aspNet.WebApi.Client**.

**Para executar a amostra de código**

1. Publique a experiência "Sample 1: Download dataset from UCI: Adult 2 class dataset", parte da recolha de amostras machine learning.
2. Atribua apiKey com a chave de um serviço Web. Consulte **a chave de autorização** acima.
3. Atribuir serviçoUri com o Pedido URI.

**Aqui está o que um pedido completo vai parecer.**
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
Para ligar a um serviço Web machine learning, utilize a biblioteca **urllib2** para a biblioteca Python 2.X e **urllib.request** para Python 3.X. Passará o ScoreData, que contém um FeatureVector, um vetor ndimensional de características numéricas que representa o ScoreData. Autentica-se o serviço de Machine Learning com uma chave API.

**Para executar a amostra de código**

1. Implementar a experiência "Sample 1: Download dataset from UCI: Adult 2 class dataset", parte da recolha de amostras machine learning.
2. Atribua apiKey com a chave de um serviço Web. Consulte a secção **chave de autorização** perto do início deste artigo.
3. Atribuir serviçoUri com o Pedido URI.

**Aqui está o que um pedido completo vai parecer.**
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

Para ligar a um Serviço Web de Machine Learning, utilize as bibliotecas **RCurl** e **rjson** para fazer o pedido e processar a resposta JSON devolvida. Passará o ScoreData, que contém um FeatureVector, um vetor ndimensional de características numéricas que representa o ScoreData. Autentica-se o serviço de Machine Learning com uma chave API.

**Aqui está o que um pedido completo vai parecer.**
```r
library("curl")
library("httr")
library("rjson")

requestFailed = function(response) {
    return (response$status_code >= 400)
}

printHttpResult = function(response, result) {
    if (requestFailed(response)) {
        print(paste("The request failed with status code:", response$status_code, sep=" "))
    
        # Print the headers - they include the request ID and the timestamp, which are useful for debugging the failure
        print(response$headers)
    }
    
    print("Result:") 
    print(fromJSON(result))  
}

req = list(
        Inputs = list( 
            "input1" = list(
                "ColumnNames" = list("Col1", "Col2", "Col3"),
                "Values" = list( list( "0", "value", "0" ),  list( "0", "value", "0" )  )
            )                ),
        GlobalParameters = setNames(fromJSON('{}'), character(0))
)

body = enc2utf8(toJSON(req))
api_key = "abc123" # Replace this with the API key for the web service
authz_hdr = paste('Bearer', api_key, sep=' ')

response = POST(url= "<your-api-uri>",
        add_headers("Content-Type" = "application/json", "Authorization" = authz_hdr),
        body = body)

result = content(response, type="text", encoding="UTF-8")

printHttpResult(response, result)
```

### <a name="javascript-sample"></a>Amostra javaScript

Para se conectar a um Serviço Web machine learning, utilize o pacote npm **pedido** no seu projeto. Também utilizará o `JSON` objeto para formatar a sua entrada e analisar o resultado. Instale-o utilizando `npm install request --save` , ou adicione ao seu package.jsem baixo e executado `"request": "*"` `dependencies` `npm install` .

**Aqui está o que um pedido completo vai parecer.**
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
