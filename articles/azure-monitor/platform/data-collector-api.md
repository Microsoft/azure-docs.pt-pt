---
title: Azure Monitor HTTP Data Collector API Microsoft Docs
description: Pode utilizar a Azure Monitor HTTP Data Collector API para adicionar dados POST JSON a um espaço de trabalho Log Analytics de qualquer cliente que possa ligar para a API REST. Este artigo descreve como usar a API, e tem exemplos de como publicar dados usando diferentes linguagens de programação.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/14/2020
ms.openlocfilehash: 94abc54c63b7d2a9998cffe7cf1396f81a26a5a1
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2020
ms.locfileid: "92107985"
---
# <a name="send-log-data-to-azure-monitor-with-the-http-data-collector-api-public-preview"></a>Envie dados de registo para O Monitor de Azure com a API do Colecionador de Dados HTTP (pré-visualização pública)
Este artigo mostra-lhe como utilizar a API de Retorno de Dados HTTP para enviar dados de registo para o Azure Monitor a partir de um cliente REST API.  Descreve como formatar dados recolhidos pelo seu script ou aplicação, incluí-lo num pedido, e ter esse pedido autorizado pelo Azure Monitor.  Exemplos são fornecidos para PowerShell, C#e Python.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

> [!NOTE]
> A Azure Monitor HTTP Data Collector API está em pré-visualização pública.

## <a name="concepts"></a>Conceitos
Pode utilizar a API do Retorno de Dados HTTP para enviar dados de registo para um espaço de trabalho log Analytics em Azure Monitor a partir de qualquer cliente que possa ligar para uma API REST.  Este pode ser um runbook na Azure Automation que recolhe dados de gestão do Azure ou de outra nuvem, ou pode ser um sistema de gestão alternativo que usa o Azure Monitor para consolidar e analisar dados de registo.

Todos os dados no espaço de trabalho Log Analytics são armazenados como um registo com um determinado tipo de registo.  Você forma os seus dados para enviar para a HTTP Data Collector API como vários registos em JSON.  Quando envia os dados, é criado um registo individual no repositório para cada registo na carga útil do pedido.


![Visão geral do colecionador de dados HTTP](media/data-collector-api/overview.png)



## <a name="create-a-request"></a>Criar um pedido
Para utilizar a API do Recotor de Dados HTTP, cria um pedido DE POST que inclui os dados a enviar na Notação de Objetos JavaScript (JSON).  As próximas três tabelas listam os atributos necessários para cada pedido. Descrevemos cada atributo com mais detalhes mais tarde no artigo.

### <a name="request-uri"></a>URI do pedido
| Atributo | Propriedade |
|:--- |:--- |
| Método |POST |
| URI |https:// \<CustomerId\> .ods.opinsights.azure.com/api/logs?api-version=2016-04-01 |
| Tipo do conteúdo |application/json |

### <a name="request-uri-parameters"></a>Solicite parâmetros URI
| Parâmetro | Descrição |
|:--- |:--- |
| IDDeCliente |O identificador único para o espaço de trabalho Log Analytics. |
| Recurso |O nome do recurso API: /api/logs. |
| Versão da API |A versão da API para usar com este pedido. Atualmente, é 2016-04-01. |

### <a name="request-headers"></a>Cabeçalhos do pedido
| Cabeçalho | Description |
|:--- |:--- |
| Autorização |A assinatura da autorização. Mais tarde no artigo, pode ler sobre como criar um cabeçalho HMAC-SHA256. |
| Log-Type |Especifique o tipo de registo dos dados que estão a ser submetidos. Só pode conter letras, números e sublinhar (_), e não pode exceder 100 caracteres. |
| x-ms-data |A data em que o pedido foi processado, no formato RFC 1123. |
| x-ms-AzureResourceId | ID de recursos do recurso Azure os dados devem ser associados. Isto povoa a [propriedade _ResourceId](./log-standard-columns.md#_resourceid) e permite que os dados sejam incluídos em consultas de contexto [de recursos.](design-logs-deployment.md#access-mode) Se este campo não for especificado, os dados não serão incluídos em consultas de contexto de recursos. |
| campo gerado pelo tempo | O nome de um campo nos dados que contém o tempotad do item de dados. Se especificar um campo, o seu conteúdo é utilizado para **o TimeGenerated**. Se este campo não for especificado, o padrão para **TimeGenerated** é o momento em que a mensagem é ingerida. O conteúdo do campo de mensagens deve seguir o formato ISO 8601 YYYY-MM-DDThh:mm:ssZ. |

## <a name="authorization"></a>Autorização
Qualquer pedido à Azure Monitor HTTP Data Collector API deve incluir um cabeçalho de autorização. Para autenticar um pedido, tem de assinar o pedido com a chave primária ou secundária para o espaço de trabalho que está a fazer o pedido. Então, passe a assinatura como parte do pedido.   

Aqui está o formato para o cabeçalho de autorização:

```
Authorization: SharedKey <WorkspaceID>:<Signature>
```

*WorkspaceID* é o identificador único para o espaço de trabalho Log Analytics. *Signature* é um [Código de Autenticação de Mensagens (HMAC) baseado em Hash](/dotnet/api/system.security.cryptography.hmacsha256?view=netcore-3.1) que é construído a partir do pedido e depois calculado utilizando o [algoritmo SHA256](/dotnet/api/system.security.cryptography.sha256?view=netcore-3.1). Em seguida, codifica-se utilizando a codificação Base64.

Utilize este formato para codificar a cadeia **de assinaturas SharedKey:**

```
StringToSign = VERB + "\n" +
                  Content-Length + "\n" +
               Content-Type + "\n" +
                  x-ms-date + "\n" +
                  "/api/logs";
```

Aqui está um exemplo de uma corda de assinatura:

```
POST\n1024\napplication/json\nx-ms-date:Mon, 04 Apr 2016 08:00:00 GMT\n/api/logs
```

Quando tiver a cadeia de assinatura, codifique-a utilizando o algoritmo HMAC-SHA256 na cadeia codificada UTF-8 e, em seguida, codifique o resultado como Base64. Utilize este formato:

```
Signature=Base64(HMAC-SHA256(UTF8(StringToSign)))
```

As amostras nas secções seguintes têm código de amostra para ajudá-lo a criar um cabeçalho de autorização.

## <a name="request-body"></a>Corpo do pedido
O corpo da mensagem deve estar no JSON. Deve incluir um ou mais registos com o nome da propriedade e pares de valor no seguinte formato. O nome da propriedade só pode conter letras, números e sublinhados (_).

```json
[
    {
        "property 1": "value1",
        "property 2": "value2",
        "property 3": "value3",
        "property 4": "value4"
    }
]
```

Pode reunir vários registos num único pedido utilizando o seguinte formato. Todos os registos devem ser do mesmo tipo de registo.

```json
[
    {
        "property 1": "value1",
        "property 2": "value2",
        "property 3": "value3",
        "property 4": "value4"
    },
    {
        "property 1": "value1",
        "property 2": "value2",
        "property 3": "value3",
        "property 4": "value4"
    }
]
```

## <a name="record-type-and-properties"></a>Tipo e propriedades de registo
Define um tipo de registo personalizado quando submete dados através da API do Azure Monitor HTTP Data Collector. Atualmente, não é possível escrever dados para os tipos de registo existentes que foram criados por outros tipos de dados e soluções. O Azure Monitor lê os dados de entrada e, em seguida, cria propriedades que correspondem aos tipos de dados dos valores que introduz.

Cada pedido à API do Colecionador de Dados deve incluir um cabeçalho **do tipo de registo** com o nome do tipo de registo. O sufixo **_CL** é automaticamente anexado ao nome que introduz para distingui-lo de outros tipos de registo como um registo personalizado. Por exemplo, se introduzir o nome **MyNewRecordType**, O Azure Monitor cria um registo com o tipo **MyNewRecordType_CL**. Isto ajuda a garantir que não existem conflitos entre os nomes de tipo criados pelo utilizador e os enviados em soluções microsoft atuais ou futuras.

Para identificar o tipo de dados de uma propriedade, o Azure Monitor adiciona um sufixo ao nome da propriedade. Se uma propriedade contiver um valor nulo, a propriedade não está incluída nesse registo. Esta tabela lista o tipo de dados de propriedade e o sufixo correspondente:

| Tipo de dados de propriedade | Sufixo |
|:--- |:--- |
| String |_s |
| Booleano |_b |
| Double (Duplo) |_d |
| Data/hora |_t |
| GUID (armazenado como uma corda) |_g |

> [!NOTE]
> Os valores de cadeia que parecem ser GUIDs receberão o sufixo de _g e formatados como UM GUID, mesmo que o valor de entrada não inclua traços. Por exemplo, tanto "8145d822-13a7-44ad-859c-36f31a84f6dd" e "8145d82213a744ad8" e "8145d82213a744ad8" O 59c36f31a84f6dd" será armazenado como "8145d822-13a7-44ad-859c-36f31a84f6dd". As únicas diferenças entre esta e outra corda são a _g no nome e a inserção de traços se não forem fornecidas na entrada. 

O tipo de dados que o Azure Monitor utiliza para cada propriedade depende se o tipo de registo para o novo registo já existe.

* Se o tipo de registo não existir, o Azure Monitor cria um novo utilizando a inferência do tipo JSON para determinar o tipo de dados de cada propriedade para o novo registo.
* Se o tipo de registo existir, o Azure Monitor tenta criar um novo registo baseado nas propriedades existentes. Se o tipo de dados de uma propriedade no novo registo não corresponder e não puder ser convertido para o tipo existente, ou se o registo inclui uma propriedade que não existe, o Azure Monitor cria uma nova propriedade que tem o sufixo relevante.

Por exemplo, esta submissão criaria um registo com três propriedades, **number_d,** **boolean_b**e **string_s:**

![Registo da amostra 1](media/data-collector-api/record-01.png)

Se então submeteu esta próxima entrada, com todos os valores formatados como cordas, as propriedades não mudariam. Estes valores podem ser convertidos para tipos de dados existentes:

![Registo da amostra 2](media/data-collector-api/record-02.png)

Mas, se então fizesse esta próxima submissão, o Azure Monitor criaria as novas propriedades **boolean_d** e **string_d.** Estes valores não podem ser convertidos:

![Registo da amostra 3](media/data-collector-api/record-03.png)

Se então submeteu a seguinte entrada, antes da criação do tipo de gravação, o Azure Monitor criaria um registo com três propriedades, **number_s,** **boolean_s**e **string_s**. Nesta entrada, cada um dos valores iniciais é formatado como uma cadeia:

![Registo da amostra 4](media/data-collector-api/record-04.png)

## <a name="reserved-properties"></a>Propriedades reservadas
As seguintes propriedades são reservadas e não devem ser utilizadas num tipo de registo personalizado. Receberá um erro se a sua carga útil incluir algum destes nomes de propriedade.

- inquilino

## <a name="data-limits"></a>Limites de dados
Existem alguns constrangimentos em torno dos dados publicados na AZure Monitor Data collection API.

* Máximo de 30 MB por post para Azure Monitor Data Collector API. Este é um limite de tamanho para um único poste. Se os dados de um único post que exceda 30 MB, deve dividir os dados até pedaços de tamanho menor e enviá-los simultaneamente.
* Limite máximo de 32 KB para valores de campo. Se o valor de campo for superior a 32 KB, os dados serão truncados.
* O número máximo recomendado de campos para um determinado tipo é de 50. Este é um limite prático do ponto de vista da usabilidade e experiência de pesquisa.  
* Uma tabela num espaço de trabalho Log Analytics suporta apenas até 500 colunas (referidas como um campo neste artigo). 
* O número máximo de caracteres para o nome da coluna é de 500.

## <a name="return-codes"></a>Códigos de devolução
O código de estado HTTP 200 significa que o pedido foi recebido para processamento. Isto indica que a operação foi concluída com sucesso.

Esta tabela lista o conjunto completo de códigos de estado que o serviço pode devolver:

| Código | Estado | Código de erro | Description |
|:--- |:--- |:--- |:--- |
| 200 |OK | |O pedido foi aceite com sucesso. |
| 400 |Mau pedido |InactiveCustomer |O espaço de trabalho foi fechado. |
| 400 |Mau pedido |InvalidApiVersion |A versão API que especificou não foi reconhecida pelo serviço. |
| 400 |Mau pedido |InvalidCustomerId |O ID do espaço de trabalho especificado é inválido. |
| 400 |Mau pedido |InvalidDataFormat |A JSON inválida foi submetida. O corpo de resposta pode conter mais informações sobre como resolver o erro. |
| 400 |Mau pedido |InvalidLogType |O tipo de registo especificado continha caracteres especiais ou numéricos. |
| 400 |Mau pedido |MissingApiVersion |A versão da API não foi especificada. |
| 400 |Mau pedido |DesaparecidoContentType |O tipo de conteúdo não foi especificado. |
| 400 |Mau pedido |MissingLogType |O tipo de registo de valor necessário não foi especificado. |
| 400 |Mau pedido |Não suportadoContentType |O tipo de conteúdo não foi definido para **aplicação/json**. |
| 403 |Proibido |InvalidAuthorization |O serviço não autuou o pedido. Verifique se o ID do espaço de trabalho e a chave de ligação são válidos. |
| 404 |Não encontrado | | Ou o URL fornecido está incorreto, ou o pedido é muito grande. |
| 429 |Muitos pedidos | | O serviço está a experimentar um elevado volume de dados da sua conta. Por favor, re-tentar o pedido mais tarde. |
| 500 |Erro interno do servidor |Circulador não especificado |O serviço encontrou um erro interno. Por favor, re-recandidam o pedido. |
| 503 |Serviço Indisponível |ServiceUnavailable |O serviço não está disponível para receber pedidos. Por favor, relemisse o seu pedido. |

## <a name="query-data"></a>Consultar dados
Para consultar os dados submetidos pela Azure Monitor HTTP Data Collector API, procure registos com **Tipo** que seja igual ao valor **do LogType** que especificou, anexado a **_CL**. Por exemplo, se usasse **o MyCustomLog,** retornaria todos os registos com `MyCustomLog_CL` .

## <a name="sample-requests"></a>Pedidos de amostra
Nas secções seguintes, encontrará amostras de como enviar dados para a Azure Monitor HTTP Data Collector API utilizando diferentes linguagens de programação.

Para cada amostra, faça estes passos para definir as variáveis para o cabeçalho de autorização:

1. No portal Azure, localize o seu espaço de trabalho Log Analytics.
2. Selecione **gestão de agentes.**
2. À direita do **ID**do espaço de trabalho, selecione o ícone de cópia e, em seguida, cole o ID como o valor da variável **de ID** do Cliente.
3. À direita da **Chave Primária,** selecione o ícone de cópia e, em seguida, cole o ID como o valor da variável **Chave Partilhada.**

Em alternativa, pode alterar as variáveis para o tipo de registo e dados JSON.

### <a name="powershell-sample"></a>Exemplo do PowerShell
```powershell
# Replace with your Workspace ID
$CustomerId = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"  

# Replace with your Primary Key
$SharedKey = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"

# Specify the name of the record type that you'll be creating
$LogType = "MyRecordType"

# You can use an optional field to specify the timestamp from the data. If the time field is not specified, Azure Monitor assumes the time is the message ingestion time
$TimeStampField = ""


# Create two records with the same set of properties to create
$json = @"
[{  "StringValue": "MyString1",
    "NumberValue": 42,
    "BooleanValue": true,
    "DateValue": "2019-09-12T20:00:00.625Z",
    "GUIDValue": "9909ED01-A74C-4874-8ABF-D2678E3AE23D"
},
{   "StringValue": "MyString2",
    "NumberValue": 43,
    "BooleanValue": false,
    "DateValue": "2019-09-12T20:00:00.625Z",
    "GUIDValue": "8809ED01-A74C-4874-8ABF-D2678E3AE23D"
}]
"@

# Create the function to create the authorization signature
Function Build-Signature ($customerId, $sharedKey, $date, $contentLength, $method, $contentType, $resource)
{
    $xHeaders = "x-ms-date:" + $date
    $stringToHash = $method + "`n" + $contentLength + "`n" + $contentType + "`n" + $xHeaders + "`n" + $resource

    $bytesToHash = [Text.Encoding]::UTF8.GetBytes($stringToHash)
    $keyBytes = [Convert]::FromBase64String($sharedKey)

    $sha256 = New-Object System.Security.Cryptography.HMACSHA256
    $sha256.Key = $keyBytes
    $calculatedHash = $sha256.ComputeHash($bytesToHash)
    $encodedHash = [Convert]::ToBase64String($calculatedHash)
    $authorization = 'SharedKey {0}:{1}' -f $customerId,$encodedHash
    return $authorization
}


# Create the function to create and post the request
Function Post-LogAnalyticsData($customerId, $sharedKey, $body, $logType)
{
    $method = "POST"
    $contentType = "application/json"
    $resource = "/api/logs"
    $rfc1123date = [DateTime]::UtcNow.ToString("r")
    $contentLength = $body.Length
    $signature = Build-Signature `
        -customerId $customerId `
        -sharedKey $sharedKey `
        -date $rfc1123date `
        -contentLength $contentLength `
        -method $method `
        -contentType $contentType `
        -resource $resource
    $uri = "https://" + $customerId + ".ods.opinsights.azure.com" + $resource + "?api-version=2016-04-01"

    $headers = @{
        "Authorization" = $signature;
        "Log-Type" = $logType;
        "x-ms-date" = $rfc1123date;
        "time-generated-field" = $TimeStampField;
    }

    $response = Invoke-WebRequest -Uri $uri -Method $method -ContentType $contentType -Headers $headers -Body $body -UseBasicParsing
    return $response.StatusCode

}

# Submit the data to the API endpoint
Post-LogAnalyticsData -customerId $customerId -sharedKey $sharedKey -body ([System.Text.Encoding]::UTF8.GetBytes($json)) -logType $logType  
```

### <a name="c-sample"></a>Exemplo C#
```csharp
using System;
using System.Net;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Security.Cryptography;
using System.Text;
using System.Threading.Tasks;

namespace OIAPIExample
{
    class ApiExample
    {
        // An example JSON object, with key/value pairs
        static string json = @"[{""DemoField1"":""DemoValue1"",""DemoField2"":""DemoValue2""},{""DemoField3"":""DemoValue3"",""DemoField4"":""DemoValue4""}]";

        // Update customerId to your Log Analytics workspace ID
        static string customerId = "xxxxxxxx-xxx-xxx-xxx-xxxxxxxxxxxx";

        // For sharedKey, use either the primary or the secondary Connected Sources client authentication key   
        static string sharedKey = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx";

        // LogName is name of the event type that is being submitted to Azure Monitor
        static string LogName = "DemoExample";

        // You can use an optional field to specify the timestamp from the data. If the time field is not specified, Azure Monitor assumes the time is the message ingestion time
        static string TimeStampField = "";

        static void Main()
        {
            // Create a hash for the API signature
            var datestring = DateTime.UtcNow.ToString("r");
            var jsonBytes = Encoding.UTF8.GetBytes(json);
            string stringToHash = "POST\n" + jsonBytes.Length + "\napplication/json\n" + "x-ms-date:" + datestring + "\n/api/logs";
            string hashedString = BuildSignature(stringToHash, sharedKey);
            string signature = "SharedKey " + customerId + ":" + hashedString;

            PostData(signature, datestring, json);
        }

        // Build the API signature
        public static string BuildSignature(string message, string secret)
        {
            var encoding = new System.Text.ASCIIEncoding();
            byte[] keyByte = Convert.FromBase64String(secret);
            byte[] messageBytes = encoding.GetBytes(message);
            using (var hmacsha256 = new HMACSHA256(keyByte))
            {
                byte[] hash = hmacsha256.ComputeHash(messageBytes);
                return Convert.ToBase64String(hash);
            }
        }

        // Send a request to the POST API endpoint
        public static void PostData(string signature, string date, string json)
        {
            try
            {
                string url = "https://" + customerId + ".ods.opinsights.azure.com/api/logs?api-version=2016-04-01";

                System.Net.Http.HttpClient client = new System.Net.Http.HttpClient();
                client.DefaultRequestHeaders.Add("Accept", "application/json");
                client.DefaultRequestHeaders.Add("Log-Type", LogName);
                client.DefaultRequestHeaders.Add("Authorization", signature);
                client.DefaultRequestHeaders.Add("x-ms-date", date);
                client.DefaultRequestHeaders.Add("time-generated-field", TimeStampField);

                System.Net.Http.HttpContent httpContent = new StringContent(json, Encoding.UTF8);
                httpContent.Headers.ContentType = new MediaTypeHeaderValue("application/json");
                Task<System.Net.Http.HttpResponseMessage> response = client.PostAsync(new Uri(url), httpContent);

                System.Net.Http.HttpContent responseContent = response.Result.Content;
                string result = responseContent.ReadAsStringAsync().Result;
                Console.WriteLine("Return Result: " + result);
            }
            catch (Exception excep)
            {
                Console.WriteLine("API Post Exception: " + excep.Message);
            }
        }
    }
}

```

### <a name="python-2-sample"></a>Amostra python 2
```python
import json
import requests
import datetime
import hashlib
import hmac
import base64

# Update the customer ID to your Log Analytics workspace ID
customer_id = 'xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'

# For the shared key, use either the primary or the secondary Connected Sources client authentication key   
shared_key = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"

# The log type is the name of the event that is being submitted
log_type = 'WebMonitorTest'

# An example JSON web monitor object
json_data = [{
   "slot_ID": 12345,
    "ID": "5cdad72f-c848-4df0-8aaa-ffe033e75d57",
    "availability_Value": 100,
    "performance_Value": 6.954,
    "measurement_Name": "last_one_hour",
    "duration": 3600,
    "warning_Threshold": 0,
    "critical_Threshold": 0,
    "IsActive": "true"
},
{   
    "slot_ID": 67890,
    "ID": "b6bee458-fb65-492e-996d-61c4d7fbb942",
    "availability_Value": 100,
    "performance_Value": 3.379,
    "measurement_Name": "last_one_hour",
    "duration": 3600,
    "warning_Threshold": 0,
    "critical_Threshold": 0,
    "IsActive": "false"
}]
body = json.dumps(json_data)

#####################
######Functions######  
#####################

# Build the API signature
def build_signature(customer_id, shared_key, date, content_length, method, content_type, resource):
    x_headers = 'x-ms-date:' + date
    string_to_hash = method + "\n" + str(content_length) + "\n" + content_type + "\n" + x_headers + "\n" + resource
    bytes_to_hash = bytes(string_to_hash).encode('utf-8')  
    decoded_key = base64.b64decode(shared_key)
    encoded_hash = base64.b64encode(hmac.new(decoded_key, bytes_to_hash, digestmod=hashlib.sha256).digest())
    authorization = "SharedKey {}:{}".format(customer_id,encoded_hash)
    return authorization

# Build and send a request to the POST API
def post_data(customer_id, shared_key, body, log_type):
    method = 'POST'
    content_type = 'application/json'
    resource = '/api/logs'
    rfc1123date = datetime.datetime.utcnow().strftime('%a, %d %b %Y %H:%M:%S GMT')
    content_length = len(body)
    signature = build_signature(customer_id, shared_key, rfc1123date, content_length, method, content_type, resource)
    uri = 'https://' + customer_id + '.ods.opinsights.azure.com' + resource + '?api-version=2016-04-01'

    headers = {
        'content-type': content_type,
        'Authorization': signature,
        'Log-Type': log_type,
        'x-ms-date': rfc1123date
    }

    response = requests.post(uri,data=body, headers=headers)
    if (response.status_code >= 200 and response.status_code <= 299):
        print 'Accepted'
    else:
        print "Response code: {}".format(response.status_code)

post_data(customer_id, shared_key, body, log_type)
```

### <a name="python-3-sample"></a>Amostra python 3
```python
import json
import requests
import datetime
import hashlib
import hmac
import base64

# Update the customer ID to your Log Analytics workspace ID
customer_id = 'xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'

# For the shared key, use either the primary or the secondary Connected Sources client authentication key   
shared_key = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"

# The log type is the name of the event that is being submitted
log_type = 'WebMonitorTest'

# An example JSON web monitor object
json_data = [{
   "slot_ID": 12345,
    "ID": "5cdad72f-c848-4df0-8aaa-ffe033e75d57",
    "availability_Value": 100,
    "performance_Value": 6.954,
    "measurement_Name": "last_one_hour",
    "duration": 3600,
    "warning_Threshold": 0,
    "critical_Threshold": 0,
    "IsActive": "true"
},
{   
    "slot_ID": 67890,
    "ID": "b6bee458-fb65-492e-996d-61c4d7fbb942",
    "availability_Value": 100,
    "performance_Value": 3.379,
    "measurement_Name": "last_one_hour",
    "duration": 3600,
    "warning_Threshold": 0,
    "critical_Threshold": 0,
    "IsActive": "false"
}]
body = json.dumps(json_data)

#####################
######Functions######  
#####################

# Build the API signature
def build_signature(customer_id, shared_key, date, content_length, method, content_type, resource):
    x_headers = 'x-ms-date:' + date
    string_to_hash = method + "\n" + str(content_length) + "\n" + content_type + "\n" + x_headers + "\n" + resource
    bytes_to_hash = bytes(string_to_hash, encoding="utf-8")  
    decoded_key = base64.b64decode(shared_key)
    encoded_hash = base64.b64encode(hmac.new(decoded_key, bytes_to_hash, digestmod=hashlib.sha256).digest()).decode()
    authorization = "SharedKey {}:{}".format(customer_id,encoded_hash)
    return authorization

# Build and send a request to the POST API
def post_data(customer_id, shared_key, body, log_type):
    method = 'POST'
    content_type = 'application/json'
    resource = '/api/logs'
    rfc1123date = datetime.datetime.utcnow().strftime('%a, %d %b %Y %H:%M:%S GMT')
    content_length = len(body)
    signature = build_signature(customer_id, shared_key, rfc1123date, content_length, method, content_type, resource)
    uri = 'https://' + customer_id + '.ods.opinsights.azure.com' + resource + '?api-version=2016-04-01'

    headers = {
        'content-type': content_type,
        'Authorization': signature,
        'Log-Type': log_type,
        'x-ms-date': rfc1123date
    }

    response = requests.post(uri,data=body, headers=headers)
    if (response.status_code >= 200 and response.status_code <= 299):
        print('Accepted')
    else:
        print("Response code: {}".format(response.status_code))

post_data(customer_id, shared_key, body, log_type)
```


## <a name="alternatives-and-considerations"></a>Alternativas e considerações
Embora a API do Colecionador de Dados deva cobrir a maioria das suas necessidades de recolher dados de formulário livre em Registos Azure, existem casos em que uma alternativa pode ser necessária para superar algumas das limitações da API. Todas as suas opções são as seguintes, considerações importantes incluídas:

| Alternativa | Description | Mais adequado para |
|---|---|---|
| [Eventos personalizados](../app/api-custom-events-metrics.md?toc=%2Fazure%2Fazure-monitor%2Ftoc.json#properties): Ingestão baseada em SDK nativo em Insights de Aplicação | O Application Insights, tipicamente instrumentado através de um SDK dentro da sua aplicação, oferece a capacidade de enviar dados personalizados através de Eventos Personalizados. | <ul><li> Dados que são gerados dentro da sua aplicação, mas não recolhidos pela SDK através de um dos tipos de dados predefinidos (pedidos, dependências, exceções, e assim por diante).</li><li> Dados que são mais frequentemente correlacionados com outros dados de aplicações em Insights de Aplicação </li></ul> |
| API do Colecionador de Dados em Registos monitores Azure | A API do Colecionador de Dados em Registos monitores Azure é uma forma completamente aberta de ingerir dados. Qualquer dado formatado num objeto JSON pode ser enviado aqui. Uma vez enviado, será processado e disponível em Logs para ser correlacionado com outros dados em Logs ou contra outros dados de Insights de Aplicação. <br/><br/> É bastante fácil fazer o upload dos dados como ficheiros para uma bolha Azure Blob, de onde estes ficheiros serão processados e enviados para o Log Analytics. Consulte [este](./create-pipeline-datacollector-api.md) artigo para obter uma amostra de tal oleoduto. | <ul><li> Dados que não são necessariamente gerados dentro de uma aplicação instrumentada no Application Insights.</li><li> Exemplos incluem tabelas de procura e de factos, dados de referência, estatísticas pré-agregadas, e assim por diante. </li><li> Destina-se a dados que serão cruzados com outros dados do Azure Monitor (Application Insights, outros tipos de dados de Registos, Centro de Segurança, Monitor Azure para Contentores/VMs, e assim por diante). </li></ul> |
| [Azure Data Explorer](/azure/data-explorer/ingest-data-overview) | Azure Data Explorer (ADX) é a plataforma de dados que alimenta o Application Insights Analytics e o Azure Monitor Logs. Agora geralmente disponível ("GA"), utilizando a plataforma de dados na sua forma bruta, proporciona-lhe total flexibilidade (mas requerendo a sobrecarga de gestão) sobre o cluster (RBAC, taxa de retenção, esquema, e assim por diante). O ADX fornece muitas [opções de ingestão,](/azure/data-explorer/ingest-data-overview#ingestion-methods) incluindo [ficheiros CSV, TSV e JSON.](/azure/kusto/management/mappings?branch=master) | <ul><li> Dados que não serão correlacionados com quaisquer outros dados no âmbito de Insights de Aplicação ou Registos. </li><li> Dados que requerem capacidades avançadas de ingestão ou processamento não disponíveis hoje em dia nos Registos do Monitor Azure. </li></ul> |


## <a name="next-steps"></a>Passos seguintes
- Utilize a [API de Pesquisa de Registo](../log-query/log-query-overview.md) para obter dados do espaço de trabalho Do Log Analytics.

- Saiba mais sobre como [criar um pipeline de dados com a API do Colecionador de Dados](create-pipeline-datacollector-api.md) utilizando o fluxo de trabalho de Apps Lógicas para o Azure Monitor.