---
title: Começar com tradução de documentos
description: Como criar um serviço de Tradução documental utilizando linguagens e plataformas de programação C#, Go, Java, Node.js ou Python
ms.topic: how-to
manager: nitinme
ms.author: lajanuar
author: laujan
ms.date: 02/11/2021
ms.openlocfilehash: 5508ffc758b08642b05b1f77b66c9f29be1c85a2
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/18/2021
ms.locfileid: "100650784"
---
# <a name="get-started-with-document-translation-preview"></a>Começar com tradução de documentos (pré-visualização)

 Neste artigo, você aprenderá a usar a Tradução documental com métodos HTTP REST API. A Tradução documental é uma característica baseada na nuvem do serviço [Azure Tradutor.](../translator-info-overview.md)  A API de Tradução documental permite a tradução de documentos inteiros, preservando simultaneamente a estrutura do documento de origem e a formatação de texto.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, vai precisar de:

* Uma [**conta Azure**](https://azure.microsoft.com/free/cognitive-services/)ativa.  Se não tiver uma, pode [**criar uma conta gratuita.**](https://azure.microsoft.com/free/)

* Um recurso de serviço [**tradutor**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation) **(não** um recurso de Serviços Cognitivos). 

* Uma [**conta de armazenamento de bolhas Azure.**](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM) Todo o acesso ao Azure Storage ocorre através de uma conta de armazenamento.

> [!NOTE]
> Atualmente, a tradução documental só é suportada no recurso Tradutor (serviço único) **e não** no recurso Serviços Cognitivos (multi-serviço).

## <a name="get-your-custom-domain-name-and-subscription-key"></a>Obtenha o seu nome de domínio personalizado e chave de subscrição

> [!IMPORTANT]
>
> * Não é possível utilizar o ponto final encontrado na página de recursos do portal Azure _Keys e Endpoint,_ nem o ponto final do tradutor `api.cognitive.microsofttranslator.com` global, para fazer pedidos HTTP para Tradução documental.
> * **Todos os pedidos da API ao serviço de Tradução documental requerem um ponto final de domínio personalizado.**

### <a name="what-is-the-custom-domain-endpoint"></a>Qual é o ponto final de domínio personalizado? 

O ponto final de domínio personalizado é um URL formatado com o seu nome de recurso, nome de anfitrião e subdiretórios de Tradutor:

```http
https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1
```

### <a name="find-your-custom-domain-name"></a>Encontre o seu nome de domínio personalizado

O parâmetro **NAME-OF-YOUR-RESOURCE** (também chamado *nome de domínio personalizado)* é o valor que inseriu no campo **Nome** quando criou o seu recurso Tradutor.

:::image type="content" source="../media/instance-details.png" alt-text="Imagem do portal Azure, criar recurso, detalhes instantâneos, campo de nomes.":::

### <a name="get-your-subscription-key"></a>Obtenha a sua chave de subscrição

Os pedidos ao serviço Tradutor requerem uma chave de leitura apenas para autenticar o acesso.

1. Se criou um novo recurso, depois de ser implementado, selecione **Ir para o recurso**. Se tiver um recurso de tradução documental existente, navegue diretamente para a sua página de recursos.
1. No trilho esquerdo, sob *Gestão de Recursos,* selecione **Keys e Endpoint**.
1. Copie e cole a chave de subscrição num local conveniente, como o *Microsoft Notepad*.
1. Irá cole-lo no código abaixo para autenticar o seu pedido no serviço de Tradução documental.

:::image type="content" source="../media/translator-keys.png" alt-text="Imagem do campo de chave de subscrição no portal Azure.":::

## <a name="create-your-azure-blob-storage-containers"></a>Crie os seus recipientes de armazenamento de bolhas Azure

Terá de criar recipientes na sua [**conta de armazenamento de bolhas Azure**](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM) para obter [**ficheiros**](/azure/storage/blobs/storage-quickstart-blobs-portal#create-a-container) glossários de origem, alvo e opcional.

* **Recipiente de origem**. Este recipiente é onde faz o upload dos seus ficheiros para tradução (obrigatório).
* **Recipiente-alvo**. Este recipiente é onde os seus ficheiros traduzidos serão armazenados (obrigatório).  
* **Recipiente glossário.** Este recipiente é onde faz o upload dos seus ficheiros glossários (opcional).  

*Consulte* **fichas de acesso SAS para tradução de documentos**

O `sourceUrl` símbolo , e opcional deve incluir um `targetUrl` `glossaryUrl`  token De Assinatura de Acesso Partilhado (SAS), anexado como uma cadeia de consulta. O token pode ser atribuído ao seu recipiente ou bolhas específicas.

* O seu recipiente **de origem** ou bolha deve ter designado o acesso **à leitura** e **à lista.**
* O seu **recipiente-alvo** ou bolha deve ter designado o acesso à **escrita** e **à lista.**
* O seu recipiente ou bolha **glossário** deve ter designado **o** acesso à leitura e **à lista.**

> [!TIP]
>
> * Se estiver a traduzir **vários** ficheiros (blobs) numa operação, **delege o acesso sas ao nível do recipiente**.  
> * Se estiver a traduzir um **único** ficheiro (blob) numa operação, **delege o acesso sas ao nível da bolha**.  
>

## <a name="set-up-your-coding-platform"></a>Configurar a sua plataforma de codificação

### <a name="c"></a>[C#](#tab/csharp)

* Criar um projeto novo.
* Substitua Program.cs pelo código C# apresentado abaixo.
* Estabeleça o seu ponto final. chave de subscrição e valores de URL de contentores em Program.cs.
* Para processar os dados do JSON, adicione [Newtonsoft.Jsna embalagem utilizando .NET CLI](https://www.nuget.org/packages/Newtonsoft.Json/).
* Executar o programa a partir do diretório do projeto.

### <a name="nodejs"></a>[Node.js](#tab/javascript)

* Criar um novo projeto Node.js.
* Instale a biblioteca Axios com `npm i axios` .
* Copie a pasta do código abaixo no seu projeto.
* Deite o seu ponto final, a tecla de subscrição e os valores de URL do contentor.
* Executar o programa.

### <a name="python"></a>[Python](#tab/python)  

* Criar um projeto novo.
* Copie e cole o código de uma das amostras para o seu projeto.
* Deite o seu ponto final, a tecla de subscrição e os valores de URL do contentor.
* Executar o programa. Por exemplo: `python translate.py`.

### <a name="java"></a>[Java](#tab/java)

* Crie um diretório de trabalho para o seu projeto. Por exemplo:

```powershell
mkdir sample-project
```

* No seu diretório de projeto, crie a seguinte estrutura subdirecional:  

  src</br>
&emsp; └ principal</br>
&emsp;&emsp;&emsp;java └

```powershell
mkdir -p src/main/java/
```

**NOTA:** Os ficheiros de origem java (por exemplo, _amostra.java)_ vivem em src/main/**java**.

* No seu diretório de raiz (por exemplo, *projeto de amostra),* inicialize o seu projeto com Gradle:

```powershell
gradle init --type basic
```

* Quando solicitado para escolher um **DSL,** selecione **Kotlin**.

* Atualize o `build.gradle.kts`  ficheiro. Tenha em mente que terá de atualizar o seu `mainClassName` dependendo da amostra:

  ```java
  plugins {
    java
    application
  }
  application {
    mainClassName = "{NAME OF YOUR CLASS}"
  }
  repositories {
    mavenCentral()
  }
  dependencies {
    compile("com.squareup.okhttp:okhttp:2.5.0")
  }
  ```

* Crie um ficheiro Java no **diretório java** e copie/cole o código a partir da amostra fornecida. Não se esqueça de adicionar a sua chave de subscrição e ponto final.

* **Construa e faça a amostra a partir do diretório de raiz:**

```powershell
gradle build
gradle run
```

### <a name="go"></a>[Ir](#tab/go)  

* Criar um novo projeto Go.
* Adicione o código indicado abaixo.
* Deite o seu ponto final, a tecla de subscrição e os valores de URL do contentor.
* Guarde o ficheiro com uma extensão ".go".
* Abra uma linha de comandos num computador com o Go instalado.
* Construa o ficheiro, por exemplo: "vá construir o exemplo-código.go".
* Executar o ficheiro, por exemplo: "código de exemplo".

 ---

## <a name="make-document-translation-requests"></a>Fazer pedidos de tradução documental

Um pedido de tradução de documento de lote é submetido ao seu ponto final de serviço De Tradutor através de um pedido DEM. Se for bem sucedido, o método POST devolve um `202 Accepted`  código de resposta e o pedido de lote é criado pelo serviço.

### <a name="http-headers"></a>Cabeçalhos HTTP

Os seguintes cabeçalhos são incluídos com cada pedido de API do Tradutor de Documento:

|Cabeçalho HTTP|Description|
|---|--|
|Ocp-Apim-Subscription-Key|**Obrigatório**: O valor é a chave de subscrição Azure para o seu recurso Tradutor ou Serviços Cognitivos.|
|Content-Type|**Requerido**: Especifica o tipo de conteúdo da carga útil. Os valores aceites são aplicação/json ou charset=UTF-8.|
|Comprimento do conteúdo|**Requerido:** o comprimento do corpo solicitado.|

### <a name="post-request-body-properties"></a>POST solicita propriedades do corpo

* O corpo de pedido do POST é um objeto JSON chamado `inputs` .
* O `inputs` objeto contém endereços de ambos e  `sourceURL` `targetURL`  endereços de contentor para os seus pares de linguagem de origem e alvo e pode, opcionalmente, conter um `glossaryURL` endereço de recipiente.
* Os `prefix` campos e campos `suffix` (opcional) são utilizados para filtrar documentos no recipiente, incluindo pastas.
* É aplicado um valor para o  `glossaries`  campo (opcional) quando o documento está a ser traduzido.
* Para `targetUrl` cada língua-alvo deve ser única.

>[!NOTE]
> Se um ficheiro com o mesmo nome já existir no destino, será substituído.

### <a name="post-a-translation-request"></a>POST um pedido de tradução

> [!IMPORTANT]
>
> * Para as amostras de código, abaixo, poderá ser necessário atualizar os seguintes campos, dependendo da operação:

>> [!div class="checklist"]
>>
>> * `endpoint`
>> * `subscriptionKey`
>> * `sourceURL`
>> * `targetURL`
>> * `glossaryURL`
>> * `id`  (ID do trabalho)
>>
> * Pode encontrar o trabalho `id`  no valor URL do cabeçalho de resposta do método `Operation-Location`  POST. O último parâmetro do URL é o trabalho da **`id`** operação.  
> * Também pode utilizar um pedido GET Jobs para recuperar o trabalho `id`  para uma operação de tradução de documentos.
> * Para as amostras abaixo, você vai codificar a sua chave e ponto final onde indicado; lembre-se de remover a chave do seu código quando terminar, e nunca postá-la publicamente.  
>
> Consulte [a segurança dos Serviços Cognitivos Azure](/azure/cognitive-services/cognitive-services-security?tabs=command-line%2Ccsharp) para obter formas de armazenar e aceder às suas credenciais de forma segura.

<!-- markdownlint-disable MD024 -->
### <a name="post-request-body-without-optional-glossaryurl"></a>Corpo de pedido post sem glossário opcionalURL

```json
{
    "inputs": [
        {
            "source": {
                "sourceUrl": "<https://YOUR-SOURCE-URL-WITH-READ-LIST-ACCESS-SAS>",
                "storageSource": "AzureBlob",
                "filter": {
                    "prefix": "News",
                    "suffix": ".txt"
                },
                "language": "en"
            },
            "targets": [
                {
                    "targetUrl": "<https://YOUR-SOURCE-URL-WITH-WRITE-LIST-ACCESS-SAS>",
                    "storageSource": "AzureBlob",
                    "category": "general",
                    "language": "de"
                }
            ]
        }
    ]
}
```

### <a name="post-request-body-with-optional-glossaryurl"></a>Corpo de pedido POSTAL com glossário opcionalURL

```json
{
  "inputs":[
    {
      "source":{
        "sourceUrl":"<https://YOUR-SOURCE-URL-WITH-READ-LIST-ACCESS-SAS>",
        "storageSource":"AzureBlob",
        "filter":{
          "prefix":"News",
          "suffix":".txt"
        },
        "language":"en"
      },
      "targets":[
        {
          "targetUrl":"<https://YOUR-SOURCE-URL-WITH-WRITE-LIST-ACCESS-SAS>",
          "storageSource":"AzureBlob",
          "category":"general",
          "language":"de",
          "glossaries":[
            {
              "glossaryUrl":"<https://YOUR-GLOSSARY-URL-WITH-READ-LIST-ACCESS-SAS>",
              "format":"xliff",
              "version":"1.2"
            }
          ]
        }
      ]
    }
  ]
}
```

## <a name="_post-document-translation_-request-code-samples"></a>_Amostras de_ código de pedido de tradução de documentos postais

Envie um pedido de tradução documental de lote ao serviço de tradução.

### <a name="c"></a>[C#](#tab/csharp)

```csharp

    using System;
    using System.Net.Http;
    using System.Threading.Tasks;
    using System.Text;
    

    class Program
    {

        static readonly string route = "/batches";

        private static readonly string endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1";

        private static readonly string subscriptionKey = "<YOUR-SUBSCRIPTION-KEY>";

        static readonly string json = ("{\"inputs\": [{\"source\": {\"sourceUrl\": \"https://YOUR-SOURCE-URL-WITH-READ-LIST-ACCESS-SAS",\"storageSource\": \"AzureBlob\",\"language\": \"en\",\"filter\":{\"prefix\": \"Demo_1/\"} }, \"targets\": [{\"targetUrl\": \"https://YOUR-TARGET-URL-WITH-WRITE-LIST-ACCESS-SAS\",\"storageSource\": \"AzureBlob\",\"category\": \"general\",\"language\": \"es\"}]}]}");
        
        static async Task Main(string[] args)
        {
            using HttpClient client = new HttpClient();
            using HttpRequestMessage request = new HttpRequestMessage();
            {
            
                StringContent content = new StringContent(json, Encoding.UTF8, "application/json");

                request.Method = HttpMethod.Post;
                request.RequestUri = new Uri(endpoint + route);
                request.Headers.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
                request.Content = content;
                
                HttpResponseMessage  response = await client.SendAsync(request);
                string result = response.Content.ReadAsStringAsync().Result;
                if (response.IsSuccessStatusCode)
                {
                    Console.WriteLine($"Status code: {response.StatusCode}");
                    Console.WriteLine();
                    Console.WriteLine($"Response Headers:"); 
                    Console.WriteLine(response.Headers);
                }
                else
                    Console.Write("Error");

            }

        }

    }
}
```

### <a name="nodejs"></a>[Node.js](#tab/javascript)

```javascript

const axios = require('axios').default;

let endpoint = 'https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1';
let route = '/batches';
let subscriptionKey = '<YOUR-SUBSCRIPTION-KEY>';

let data = JSON.stringify({"inputs": [
  {
      "source": {
          "sourceUrl": "https://YOUR-SOURCE-URL-WITH-READ-LIST-ACCESS-SAS",
          "storageSource": "AzureBlob",
          "language": "en",
          "filter":{
              "prefix": "Demo_1/"
          }
      },
      "targets": [
          {
              "targetUrl": "https://YOUR-TARGET-URL-WITH-WRITE-LIST-ACCESS-SAS",
              "storageSource": "AzureBlob",
              "category": "general",
              "language": "es"}]}]});

let config = {
  method: 'post',
  baseURL: endpoint,
  url: route,
  headers: {
    'Ocp-Apim-Subscription-Key': subscriptionKey,
    'Content-Type': 'application/json'
  },
  data: data
};

axios(config)
.then(function (response) {
  let result = { statusText: response.statusText, statusCode: response.status, headers: response.headers };
  console.log()
  console.log(JSON.stringify(result));
})
.catch(function (error) {
  console.log(error);
});
```

### <a name="python"></a>[Python](#tab/python)

```python

import requests

endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1"
subscriptionKey =  '<YOUR-SUBSCRIPTION-KEY>'
path = '/batches'
constructed_url = endpoint + path

payload= {
    "inputs": [
        {
            "source": {
                "sourceUrl": "https://YOUR-SOURCE-URL-WITH-READ-LIST-ACCESS-SAS",
                "storageSource": "AzureBlob",
                "language": "en",
                "filter":{
                    "prefix": "Demo_1/"
                }
            },
            "targets": [
                {
                    "targetUrl": "https://YOUR-TARGET-URL-WITH-WRITE-LIST-ACCESS-SAS",
                    "storageSource": "AzureBlob",
                    "category": "general",
                    "language": "es"
                }
            ]
        }
    ]
}
headers = {
  'Ocp-Apim-Subscription-Key': subscriptionKey,
  'Content-Type': 'application/json'
}

response = requests.post(constructed_url, headers=headers, json=payload)

print(f'response status code: {response.status_code}\nresponse status: {response.reason}\nresponse headers: {response.headers}')
```

### <a name="java"></a>[Java](#tab/java)

```java

import java.io.*;
import java.net.*;
import java.util.*;
import com.squareup.okhttp.*;

public class DocumentTranslation {
    String subscriptionKey = "'<YOUR-SUBSCRIPTION-KEY>'";
    String endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1";
    String path = endpoint + "/batches";

    OkHttpClient client = new OkHttpClient();

    public void post() throws IOException {
        MediaType mediaType = MediaType.parse("application/json");
        RequestBody body = RequestBody.create(mediaType,  "{\n \"inputs\": [\n {\n \"source\": {\n \"sourceUrl\": \"https://YOUR-SOURCE-URL-WITH-READ-LIST-ACCESS-SAS\",\n \"filter\": {\n  \"prefix\": \"Demo_1\"\n  },\n  \"language\": \"en\",\n \"storageSource\": \"AzureBlob\"\n  },\n \"targets\": [\n {\n \"targetUrl\": \"https://YOUR-TARGET-URL-WITH-WRITE-LIST-ACCESS-SAS\",\n \"category\": \"general\",\n\"language\": \"fr\",\n\"storageSource\": \"AzureBlob\"\n }\n ],\n \"storageType\": \"Folder\"\n }\n  ]\n}");
        Request request = new Request.Builder()
                .url(path).post(body)
                .addHeader("Ocp-Apim-Subscription-Key", subscriptionKey)
                .addHeader("Content-type", "application/json")
                .build();
        Response response = client.newCall(request).execute();
        System.out.println(response.code());
        System.out.println(response.headers());
    }

  public static void main(String[] args) {
        try {
            DocumentTranslation sampleRequest = new DocumentTranslation();
            sampleRequest.post();
        } catch (Exception e) {
            System.out.println(e);
        }
    }
}
```

### <a name="go"></a>[Ir](#tab/go)

```go

package main

import (
 "bytes"
 "fmt"
"net/http"
)

func main() {
endpoint := "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1"
subscriptionKey := "<YOUR-SUBSCRIPTION-KEY>"
uri := endpoint + "/batches"
method := "POST"

var jsonStr = []byte(`{"inputs":[{"source":{"sourceUrl":"https://YOUR-SOURCE-URL-WITH-READ-LIST-ACCESS-SAS","storageSource":"AzureBlob","language":"en","filter":{"prefix":"Demo_1/"}},"targets":[{"targetUrl":"https://YOUR-TARGET-URL-WITH-WRITE-LIST-ACCESS-SAS","storageSource":"AzureBlob","category":"general","language":"es"}]}]}`)

req, err := http.NewRequest(method, endpoint, bytes.NewBuffer(jsonStr))
req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)
req.Header.Add("Content-Type", "application/json")

client := &http.Client{}

if err != nil {
    fmt.Println(err)
    return
  }
  res, err := client.Do(req)
  if err != nil {
    fmt.Println(err)
    return
  }
  defer res.Body.Close()
  fmt.Println("response status:",  res.Status)
  fmt.Println("response headers",  res.Header)
}
```

---

## <a name="_get-file-formats_-code-samples"></a>_Amostras de código de formatos de ficheiros GET_

Recupere uma lista de formatos de ficheiros suportados. Se for bem sucedido, este método devolve um `200 OK` código de resposta.

### <a name="c"></a>[C#](#tab/csharp)

```csharp
   
using System;
using System.Net.Http;
using System.Threading.Tasks;


class Program
{


    private static readonly string endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1";

    static readonly string route = "/documents/formats";

    private static readonly string subscriptionKey = "<YOUR-SUBSCRIPTION-KEY>";

    static async Task Main(string[] args)
    {

        HttpClient client = new HttpClient();
            using HttpRequestMessage request = new HttpRequestMessage();
            {
                request.Method = HttpMethod.Get;
                request.RequestUri = new Uri(endpoint + route);
                request.Headers.Add("Ocp-Apim-Subscription-Key", subscriptionKey);


                HttpResponseMessage response = await client.SendAsync(request);
                string result = response.Content.ReadAsStringAsync().Result;

                Console.WriteLine($"Status code: {response.StatusCode}");
                Console.WriteLine($"Response Headers: {response.Headers}");
                Console.WriteLine();
                Console.WriteLine(result);
            }
}
```

### <a name="nodejs"></a>[Node.js](#tab/javascript)

```javascript

const axios = require('axios');

let endpoint = 'https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1';
let subscriptionKey = '<YOUR-SUBSCRIPTION-KEY>';
let route = '/documents/formats';

let config = {
  method: 'get',
  url: endpoint + route,
  headers: { 
    'Ocp-Apim-Subscription-Key': subscriptionKey
  }
};

axios(config)
.then(function (response) {
  console.log(JSON.stringify(response.data));
})
.catch(function (error) {
  console.log(error);
});

```

### <a name="java"></a>[Java](#tab/java)

```java
import java.io.*;
import java.net.*;
import java.util.*;
import com.squareup.okhttp.*;

public class GetFileFormats {

    String subscriptionKey = "<YOUR-SUBSCRIPTION-KEY>";
    String endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1";
    String url = endpoint + "/documents/formats";
    OkHttpClient client = new OkHttpClient();

    public void get() throws IOException {
        Request request = new Request.Builder().url(
                url).method("GET", null).addHeader("Ocp-Apim-Subscription-Key", subscriptionKey).build();
        Response response = client.newCall(request).execute(); 
            System.out.println(response.body().string());
        }

    public static void main(String[] args) throws IOException {
        try{
            GetJobs jobs = new GetJobs();
            jobs.get();
        } catch (Exception e) {
            System.out.println(e);
        }
    }
}

```

### <a name="python"></a>[Python](#tab/python)

```python

import http.client

host = '<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com'
parameters = '//translator/text/batch/v1.0-preview.1/documents/formats'
subscriptionKey =  '<YOUR-SUBSCRIPTION-KEY>'
conn = http.client.HTTPSConnection(host)
payload = ''
headers = {
  'Ocp-Apim-Subscription-Key': subscriptionKey
}
conn.request("GET", parameters , payload, headers)
res = conn.getresponse()
data = res.read()
print(res.status)
print()
print(data.decode("utf-8"))
```

### <a name="go"></a>[Ir](#tab/go)

```go

package main

import (
  "fmt"
  "net/http"
  "io/ioutil"
)

func main() {

  endpoint := "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1"
  subscriptionKey := "<YOUR-SUBSCRIPTION-KEY>"
  uri := endpoint + "/documents/formats"
  method := "GET"

  client := &http.Client {
  }
  req, err := http.NewRequest(method, uri, nil)

  if err != nil {
    fmt.Println(err)
    return
  }
  req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)

  res, err := client.Do(req)
  if err != nil {
    fmt.Println(err)
    return
  }
  defer res.Body.Close()

  body, err := ioutil.ReadAll(res.Body)
  if err != nil {
    fmt.Println(err)
    return
  }
  fmt.Println(res.StatusCode)
  fmt.Println(string(body))
}
```

---

## <a name="_get-job-status_-code-samples"></a>_Amostras de_ código de estado de trabalho GET

Obtenha o estado atual de um único emprego e um resumo de todos os trabalhos num pedido de tradução documental. Se for bem sucedido, este método devolve um `200 OK` código de resposta.
<!-- markdownlint-disable MD024 -->

### <a name="c"></a>[C#](#tab/csharp)

```csharp
   
using System;
using System.Net.Http;
using System.Threading.Tasks;


class Program
{


    private static readonly string endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1";

    static readonly string route = "/batches/{id}";

    private static readonly string subscriptionKey = "<YOUR-SUBSCRIPTION-KEY>";

    static async Task Main(string[] args)
    {

        HttpClient client = new HttpClient();
            using HttpRequestMessage request = new HttpRequestMessage();
            {
                request.Method = HttpMethod.Get;
                request.RequestUri = new Uri(endpoint + route);
                request.Headers.Add("Ocp-Apim-Subscription-Key", subscriptionKey);


                HttpResponseMessage response = await client.SendAsync(request);
                string result = response.Content.ReadAsStringAsync().Result;

                Console.WriteLine($"Status code: {response.StatusCode}");
                Console.WriteLine($"Response Headers: {response.Headers}");
                Console.WriteLine();
                Console.WriteLine(result);
            }
}
```

### <a name="nodejs"></a>[Node.js](#tab/javascript)

```javascript

const axios = require('axios');

let endpoint = 'https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1';
let subscriptionKey = '<YOUR-SUBSCRIPTION-KEY>';
let route = '/batches/{id}';

let config = {
  method: 'get',
  url: endpoint + route,
  headers: { 
    'Ocp-Apim-Subscription-Key': subscriptionKey
  }
};

axios(config)
.then(function (response) {
  console.log(JSON.stringify(response.data));
})
.catch(function (error) {
  console.log(error);
});

```

### <a name="java"></a>[Java](#tab/java)

```java

import java.io.*;
import java.net.*;
import java.util.*;
import com.squareup.okhttp.*;

public class GetJobStatus {

    String subscriptionKey = "<YOUR-SUBSCRIPTION-KEY>";
    String endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1";
    String url = endpoint + "/batches/{id}";
    OkHttpClient client = new OkHttpClient();

    public void get() throws IOException {
        Request request = new Request.Builder().url(
                url).method("GET", null).addHeader("Ocp-Apim-Subscription-Key", subscriptionKey).build();
        Response response = client.newCall(request).execute(); 
            System.out.println(response.body().string());
        }

    public static void main(String[] args) throws IOException {
        try{
            GetJobs jobs = new GetJobs();
            jobs.get();
        } catch (Exception e) {
            System.out.println(e);
        }
    }
}

```

### <a name="python"></a>[Python](#tab/python)

```python

import http.client

host = '<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com'
parameters = '//translator/text/batch/v1.0-preview.1/batches/{id}'
subscriptionKey =  '<YOUR-SUBSCRIPTION-KEY>'
conn = http.client.HTTPSConnection(host)
payload = ''
headers = {
  'Ocp-Apim-Subscription-Key': subscriptionKey
}
conn.request("GET", parameters , payload, headers)
res = conn.getresponse()
data = res.read()
print(res.status)
print()
print(data.decode("utf-8"))
```

### <a name="go"></a>[Ir](#tab/go)

```go

package main

import (
  "fmt"
  "net/http"
  "io/ioutil"
)

func main() {

  endpoint := "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1"
  subscriptionKey := "<YOUR-SUBSCRIPTION-KEY>"
  uri := endpoint + "/batches/{id}"
  method := "GET"

  client := &http.Client {
  }
  req, err := http.NewRequest(method, uri, nil)

  if err != nil {
    fmt.Println(err)
    return
  }
  req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)

  res, err := client.Do(req)
  if err != nil {
    fmt.Println(err)
    return
  }
  defer res.Body.Close()

  body, err := ioutil.ReadAll(res.Body)
  if err != nil {
    fmt.Println(err)
    return
  }
  fmt.Println(res.StatusCode)
  fmt.Println(string(body))
}
```

---

## <a name="_get-document-status_-code-samples"></a>_Amostras de_ código de estado do documento GET

### <a name="brief-overview"></a>Breve visão geral

Recupere o estado de um documento específico num pedido de tradução documental. Se for bem sucedido, este método devolve um `200 OK` código de resposta.

### <a name="c"></a>[C#](#tab/csharp)

```csharp
   
using System;
using System.Net.Http;
using System.Threading.Tasks;


class Program
{


    private static readonly string endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1";

    static readonly string route = "/{id}/document/{documentId}";

    private static readonly string subscriptionKey = "<YOUR-SUBSCRIPTION-KEY>";

    static async Task Main(string[] args)
    {

        HttpClient client = new HttpClient();
            using HttpRequestMessage request = new HttpRequestMessage();
            {
                request.Method = HttpMethod.Get;
                request.RequestUri = new Uri(endpoint + route);
                request.Headers.Add("Ocp-Apim-Subscription-Key", subscriptionKey);


                HttpResponseMessage response = await client.SendAsync(request);
                string result = response.Content.ReadAsStringAsync().Result;

                Console.WriteLine($"Status code: {response.StatusCode}");
                Console.WriteLine($"Response Headers: {response.Headers}");
                Console.WriteLine();
                Console.WriteLine(result);
            }
}
```

### <a name="nodejs"></a>[Node.js](#tab/javascript)

```javascript

const axios = require('axios');

let endpoint = 'https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1';
let subscriptionKey = '<YOUR-SUBSCRIPTION-KEY>';
let route = '/{id}/document/{documentId}';

let config = {
  method: 'get',
  url: endpoint + route,
  headers: { 
    'Ocp-Apim-Subscription-Key': subscriptionKey
  }
};

axios(config)
.then(function (response) {
  console.log(JSON.stringify(response.data));
})
.catch(function (error) {
  console.log(error);
});

```

### <a name="java"></a>[Java](#tab/java)

```java

import java.io.*;
import java.net.*;
import java.util.*;
import com.squareup.okhttp.*;

public class GetDocumentStatus {

    String subscriptionKey = "<YOUR-SUBSCRIPTION-KEY>";
    String endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1";
    String url = endpoint + "/{id}/document/{documentId}";
    OkHttpClient client = new OkHttpClient();

    public void get() throws IOException {
        Request request = new Request.Builder().url(
                url).method("GET", null).addHeader("Ocp-Apim-Subscription-Key", subscriptionKey).build();
        Response response = client.newCall(request).execute(); 
            System.out.println(response.body().string());
        }

    public static void main(String[] args) throws IOException {
        try{
            GetJobs jobs = new GetJobs();
            jobs.get();
        } catch (Exception e) {
            System.out.println(e);
        }
    }
}

```

### <a name="python"></a>[Python](#tab/python)

```python

import http.client

host = '<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com'
parameters = '//translator/text/batch/v1.0-preview.1/{id}/document/{documentId}'
subscriptionKey =  '<YOUR-SUBSCRIPTION-KEY>'
conn = http.client.HTTPSConnection(host)
payload = ''
headers = {
  'Ocp-Apim-Subscription-Key': subscriptionKey
}
conn.request("GET", parameters , payload, headers)
res = conn.getresponse()
data = res.read()
print(res.status)
print()
print(data.decode("utf-8"))
```

### <a name="go"></a>[Ir](#tab/go)

```go

package main

import (
  "fmt"
  "net/http"
  "io/ioutil"
)

func main() {

  endpoint := "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1"
  subscriptionKey := "<YOUR-SUBSCRIPTION-KEY>"
  uri := endpoint + "/{id}/document/{documentId}"
  method := "GET"

  client := &http.Client {
  }
  req, err := http.NewRequest(method, uri, nil)

  if err != nil {
    fmt.Println(err)
    return
  }
  req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)

  res, err := client.Do(req)
  if err != nil {
    fmt.Println(err)
    return
  }
  defer res.Body.Close()

  body, err := ioutil.ReadAll(res.Body)
  if err != nil {
    fmt.Println(err)
    return
  }
  fmt.Println(res.StatusCode)
  fmt.Println(string(body))
}
```

---

## <a name="_delete-job_-code-samples"></a>_EXCLUIR amostras de_ código de trabalho

### <a name="brief-overview"></a>Breve visão geral

Cancelar trabalho de processamento ou fila. Apenas documentos para os quais a tradução não começou serão cancelados.

### <a name="c"></a>[C#](#tab/csharp)

```csharp
   
using System;
using System.Net.Http;
using System.Threading.Tasks;


class Program
{


    private static readonly string endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1";

    static readonly string route = "/batches/{id}";

    private static readonly string subscriptionKey = "<YOUR-SUBSCRIPTION-KEY>";

    static async Task Main(string[] args)
    {

        HttpClient client = new HttpClient();
            using HttpRequestMessage request = new HttpRequestMessage();
            {
                request.Method = HttpMethod.Delete;
                request.RequestUri = new Uri(endpoint + route);
                request.Headers.Add("Ocp-Apim-Subscription-Key", subscriptionKey);


                HttpResponseMessage response = await client.SendAsync(request);
                string result = response.Content.ReadAsStringAsync().Result;

                Console.WriteLine($"Status code: {response.StatusCode}");
                Console.WriteLine($"Response Headers: {response.Headers}");
                Console.WriteLine();
                Console.WriteLine(result);
            }
}
```

### <a name="nodejs"></a>[Node.js](#tab/javascript)

```javascript

const axios = require('axios');

let endpoint = 'https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1';
let subscriptionKey = '<YOUR-SUBSCRIPTION-KEY>';
let route = '/batches/{id}';

let config = {
  method: 'delete',
  url: endpoint + route,
  headers: { 
    'Ocp-Apim-Subscription-Key': subscriptionKey
  }
};

axios(config)
.then(function (response) {
  console.log(JSON.stringify(response.data));
})
.catch(function (error) {
  console.log(error);
});

```

### <a name="java"></a>[Java](#tab/java)

```java

import java.io.*;
import java.net.*;
import java.util.*;
import com.squareup.okhttp.*;

public class DeleteJob {

    String subscriptionKey = "<YOUR-SUBSCRIPTION-KEY>";
    String endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1";
    String url = endpoint + "/batches/{id}";
    OkHttpClient client = new OkHttpClient();

    public void get() throws IOException {
        Request request = new Request.Builder().url(
                url).method("DELETE", null).addHeader("Ocp-Apim-Subscription-Key", subscriptionKey).build();
        Response response = client.newCall(request).execute(); 
            System.out.println(response.body().string());
        }

    public static void main(String[] args) throws IOException {
        try{
            GetJobs jobs = new GetJobs();
            jobs.get();
        } catch (Exception e) {
            System.out.println(e);
        }
    }
}

```

### <a name="python"></a>[Python](#tab/python)

```python

import http.client

host = '<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com'
parameters = '//translator/text/batch/v1.0-preview.1/batches/{id}'
subscriptionKey =  '<YOUR-SUBSCRIPTION-KEY>'
conn = http.client.HTTPSConnection(host)
payload = ''
headers = {
  'Ocp-Apim-Subscription-Key': subscriptionKey
}
conn.request("DELETE", parameters , payload, headers)
res = conn.getresponse()
data = res.read()
print(res.status)
print()
print(data.decode("utf-8"))
```

### <a name="go"></a>[Ir](#tab/go)

```go

package main

import (
  "fmt"
  "net/http"
  "io/ioutil"
)

func main() {

  endpoint := "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0-preview.1"
  subscriptionKey := "<YOUR-SUBSCRIPTION-KEY>"
  uri := endpoint + "/batches/{id}"
  method := "DELETE"

  client := &http.Client {
  }
  req, err := http.NewRequest(method, uri, nil)

  if err != nil {
    fmt.Println(err)
    return
  }
  req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)

  res, err := client.Do(req)
  if err != nil {
    fmt.Println(err)
    return
  }
  defer res.Body.Close()

  body, err := ioutil.ReadAll(res.Body)
  if err != nil {
    fmt.Println(err)
    return
  }
  fmt.Println(res.StatusCode)
  fmt.Println(string(body))
}
```

---

## <a name="content-limits"></a>Limites de conteúdo

A tabela abaixo lista os limites para os dados que envia para a Tradução documental.

|Atributo | Limite|
|---|---|
|Tamanho do documento| ≤ 40 MB |
|Número total de ficheiros.|≤ 1000 |
|Tamanho total do conteúdo num lote | ≤ 250 MB|
|Número de línguas-alvo num lote| ≤ 10 |
|Tamanho do ficheiro de memória de tradução| ≤ 10 MB|

> [!NOTE]
> Os limites de conteúdo acima referidos estão sujeitos a alterações antes da publicação pública.

## <a name="learn-more"></a>Saber mais

* [Referência API tradutor v3](../reference/v3-0-reference.md)
* [Suporte de idiomas](../language-support.md)
* [Assinaturas na Azure API Management.](/azure/api-management/api-management-subscriptions)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Crie um sistema de linguagem personalizado utilizando o Tradutor Personalizado](../custom-translator/overview.md)
>
>
